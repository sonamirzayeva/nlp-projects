import dash
import pandas as pd
import numpy as np
from dash import dcc
from dash import html
from dash.dependencies import Input, Output
import dash_bootstrap_components as dbc
import plotly.express as px
from dash import dash_table
import pathlib
import base64


app = dash.Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP],suppress_callback_exceptions=True,
                meta_tags=[{'name': 'viewport',
                            'content': 'width=device-width, initial-scale=1.0'}]
                )

# get relative data folder
PATH = pathlib.Path(__file__).parent

# get the picture
test_png = 'airbnb.png'
test_base64 = base64.b64encode(open(test_png, 'rb').read()).decode('ascii')

# import data
df=pd.read_csv('airbnb_ny_listing.csv')

# create figures that don't need input for overview part
df_borough=df.groupby(['Borough']).size().reset_index(name='# of Airbnb')
fig1 = px.bar(df_borough, x='Borough', y='# of Airbnb')
fig1.update_layout(title_text='The Number of Airbnb in Each NYC Borough', title_x=0.5)
df_price = df.groupby(['Borough']).mean()['Price'].reset_index(name='Average Price')
fig2 = px.line(df_price, x='Borough', y='Average Price')
fig2.update_layout(title_text='The Average Price of Airbnb in Each NYC Borough', title_x=0.5)
df_type = df.groupby(['Room Type']).size().reset_index(name='count')
fig3 = px.pie(df_type, values='count', names='Room Type')
fig3.update_layout(title_text='The Distribution of Airbnb Type', title_x=0.5)


# styling the sidebar
SIDEBAR_STYLE = {
    "position": "fixed",
    "top": 0,
    "left": 0,
    "bottom": 0,
    "width": "16rem",
    "padding": "2rem 1rem",
    "background-color": "#f8f9fa",
    "overflow": "scroll"
}

# padding for the page content
CONTENT_STYLE = {
    "margin-left": "18rem",
    "margin-right": "2rem",
    "padding": "2rem 1rem",
}

sidebar = html.Div(
    [
        html.H4("NYC Airbnb Exploration", className="display-4",style={"fontSize": "220%","textAlign": "center"}),
        html.Hr(),
        html.P(
            "Please choose the part that you are interested in!! :)", className="lead"
        ),
        dcc.RadioItems(
            id='radio-item',
            options=[
                {'label': ' Introduction', 'value': 'intro'},
                {'label': ' Overview', 'value': 'overview'},
                {'label': ' Find the Airbnb You Want!', 'value': 'find'}],
            value='intro',
            labelStyle={'display': 'block'}
        ),
        html.Hr(),
        dcc.Markdown('''
        > MA705 Data Sceinece
        
        >@Zehui Yuan
        
        >Bentley University
        
        >MSBA 2022
        ''')

    ],
    style=SIDEBAR_STYLE,
)

content = html.Div(id="page-content",
                   children=[],
                   style=CONTENT_STYLE)

app.layout = html.Div([
    sidebar,
    content,
])

server = app.server

@app.callback(Output('page-content', 'children'),
              [Input('radio-item', 'value')])
def display_page(value):
    if value == 'intro':
        return html.Div([
            html.Div([html.Img(src='data:image/png;base64,{}'.format(test_base64),style={'height':'100%', 'width':'50%'})],
                     style={'textAlign': 'center'}),
            html.Br(),
            dcc.Tabs(id="tabs-des", value='project', children=[
                dcc.Tab(label='Project Introduction', value='project'),
                dcc.Tab(label='Data Introduction', value='data'),
                dcc.Tab(label='Reference', value='reference'),
            ]),
            html.Div(id='content')])
    if value == 'overview':
        return html.Div([
            dcc.Graph(id='type', figure=fig3),
            dcc.Graph(id='borough', figure=fig1),
            dcc.Graph(id='price', figure=fig2),

        ])

    if value == 'find':
        return html.Div([
            html.Div([
                html.Div([
                    html.Pre(children="Where would you like to live?", style={"fontSize": "120%",'font-family':'Arial'}),
                    dcc.Checklist(
                        id='borough-checklist', value=['Manhattan','Brooklyn','Bronx','Queens','Staten Island'],
                        persistence=True, persistence_type='memory',
                        options=[{'label': x, 'value': x} for x in df.Borough.unique()]
                    ),
                    html.Br()
                ], className='five columns'),
                html.Div([
                    html.Pre(children="How much would you like to pay?", style={"fontSize":"120%",'font-family':'Arial'}),
                    dcc.Dropdown(
                        id='price-dropdown', value=200, clearable=False,
                        options=[{'label':'$0~$200','value':200},
                                 {'label':'$201~$500','value':500},
                                 {'label':'$501~$1000','value':1000},
                                 {'label':'$1001~$3000','value':3000},
                                 {'label':'>$3000','value':3001}]
                    ),
                    html.Br()
                ], className='three columns'),
                html.Div([
                    html.Pre(children="What type of room you would like to live in?", style={"fontSize":"120%",'font-family':'Arial'}),
                    dcc.Dropdown(
                        id='type-dropdown', value=['Private room'], clearable=False,
                        options=[{'label': x, 'value': x} for x in df['Room Type'].unique()], multi=True,
                    )], className='three columns'),
                dcc.Graph(id='airbnb_location', figure={}),
                html.Label("Detailed Information about the Airbnb Recommended for You",style={"fontSize": "120%",'font-family':'Arial',"textAlign": "center"}),
                html.Br(),
                dcc.Markdown('''Note: Order by review rate and price. Horizontally scrollable.''', style={"fontSize":"60%",'font-family':'Arial'}),
                html.Br(),
                html.Div(id='top_rate_table')


            ], className='row')
        ])

@app.callback(Output('content', 'children'),
              Input('tabs-des', 'value'))
def render_content(tab):
    if tab == 'project':
        return html.Div([
            html.Br(),
            dcc.Markdown('''
            
            **Welcome to New York City Airbnb Recommendations!!!**
            
            In this dashboard, you can find your desired airbnbs according to your preference.
            
            There are mainly two parts of the analysis:
            * *NYC Airbnb Overview* includes basic information about airbnbs in NYC, such as the number of airbnbs and the average price of airbnbs in each borough and the composition of airbnb type.
            * *Airbnb Recommendation For You* where you can require the borough where the airbnb is located, the price of the airbnb and the type of the airbnb. The system will automatically recommend corresponding airbnbs for you.
            
            Please click the options on left-side bar to choose the analysis you'd like to explore.
            
            Let's try!!
            ''')

        ])
    elif tab == 'data':
        return html.Div([
            html.Br(),
            dcc.Markdown('''
            The airbnb data includes:
            
            * airbnb name,
            * location (borough, latitude and longitude coordinates), 
            * host information,
            * review rating
            
            The data is collected by Airbnb on 02 November, 2021. 
            
            '''),

        ])
    elif tab == 'reference':
        return html.Div([
            html.Br(),
            html.Label(['The original dataset is obtained from ', html.A('Inside Airbnb', href='http://insideairbnb.com/get-the-data.html',target='_blank'),'.']),
            html.Br(),
            html.Label(['New York City Borough Boundaries: ', html.A('click here',href='https://data.cityofnewyork.us/City-Government/Borough-Boundaries/tqmj-j8zm',target='_blank'),'.']),
            html.Br(),
            html.Label(['Side Bar Construction Guidance ', html.A('click here',href='https://dash-bootstrap-components.opensource.faculty.ai/examples/simple-sidebar/',target='_blank'),'.']),
            html.Br()
        ])



@app.callback(
    Output(component_id='airbnb_location', component_property='figure'),
    [Input(component_id='borough-checklist', component_property='value'),
     Input(component_id='price-dropdown', component_property='value'),
     Input(component_id='type-dropdown', component_property='value')])
def display_value(borough_chosen, price_chosen, type_chosen):
    df_fltrd = df[df.Borough.isin(borough_chosen)]
    if price_chosen == 200:
        df_fltrd = df_fltrd[df_fltrd['Price']<=200]
    elif price_chosen == 500:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=201)&(df_fltrd['Price']<=500)]
    elif price_chosen == 1000:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=501)&(df_fltrd['Price']<=1000)]
    elif price_chosen == 3000:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=1001)&(df_fltrd['Price']<=3000)]
    elif price_chosen == 3001:
        df_fltrd = df_fltrd[df_fltrd['Price']>=3001]
    df_fltrd = df_fltrd[df_fltrd['Room Type'].isin(type_chosen)]
    fig = px.scatter_mapbox(df_fltrd,
                            title="Map of Airbnb Recommended for You",
                            lat="latitude",
                            lon="longitude",
                            color="Borough",
                            zoom=9.24,
                            center=dict(lat=df_fltrd['latitude'].mean(),
                                        lon=df_fltrd['longitude'].mean()),
                            hover_data={
                            "id": True})

    fig.update_layout(mapbox_style="open-street-map",title_x=0.5)
    return fig

@app.callback(
    Output(component_id='top_rate_table', component_property='children'),
    [Input(component_id='borough-checklist', component_property='value'),
     Input(component_id='price-dropdown', component_property='value'),
     Input(component_id='type-dropdown', component_property='value')])
def display_value(borough_chosen, price_chosen, type_chosen):
    df_fltrd = df[df.Borough.isin(borough_chosen)]
    if price_chosen == 200:
        df_fltrd = df_fltrd[df_fltrd['Price']<=200]
    elif price_chosen == 500:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=201)&(df_fltrd['Price']<=500)]
    elif price_chosen == 1000:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=501)&(df_fltrd['Price']<=1000)]
    elif price_chosen == 3000:
        df_fltrd = df_fltrd[(df_fltrd['Price']>=1001)&(df_fltrd['Price']<=3000)]
    elif price_chosen == 3001:
        df_fltrd = df_fltrd[df_fltrd['Price']>=3001]
    df_fltrd = df_fltrd[df_fltrd['Room Type'].isin(type_chosen)]
    a = df_fltrd.sort_values(['Review Score','Price'], ascending=[False,True])
    a = a[['id','Borough','Room Type','Price','Review Score','URL']]
    data=a.to_dict('rows')
    columns=[{"name": i, "id": i,} for i in (a.columns)]

    return dash_table.DataTable(data=data,
                                columns=columns,
                                fixed_rows={'headers': True},
                                style_table={'overflowX': 'auto'},
    style_cell={'textAlign': 'left',
        'height': 'auto',
        # all three widths are needed
        'minWidth': '300px', 'width': '300px', 'maxWidth': '300px',
        'whiteSpace': 'normal'
    })


if __name__ == '__main__':
    app.run_server(debug=False)
